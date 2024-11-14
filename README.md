# ProvaJava
Java
Solicitação 
package com.suporte.model;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import java.time.LocalDateTime;

@Entity
public class Request {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long requestId;

    private String description;
    private String status;
    private LocalDateTime creationDate = LocalDateTime.now();
    private String priority;
    private Long userId;

    public Long getRequestId() {
        return requestId;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getStatus() {
        return status;
    }

    public void setStatus(String status) {
        this.status = status;
    }

    public LocalDateTime getCreationDate() {
        return creationDate;
    }

    public String getPriority() {
        return priority;
    }

    public void setPriority(String priority) {
        this.priority = priority;
    }

    public Long getUserId() {
        return userId;
    }

    public void setUserId(Long userId) {
        this.userId = userId;
    }
}


2. (Interface SolicitacaoRepository)
package com.suporte.repository;

import com.suporte.model.Solicitacao;
import org.springframework.data.jpa.repository.JpaRepository;

public interface SolicitacaoRepository extends JpaRepository<Solicitacao, Long> {
}

}
3.  (Classe SolicitacaoService)
package com.suporte.service;

import com.suporte.model.Solicitacao;
import com.suporte.repository.SolicitacaoRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.Optional;

@Service
public class SolicitacaoService {

    @Autowired
    private SolicitacaoRepository solicitacaoRepository;

    public Solicitacao criarSolicitacao(Solicitacao solicitacao) {
        return solicitacaoRepository.save(solicitacao);
    }

    public List<Solicitacao> listarSolicitacoes() {
        return solicitacaoRepository.findAll();
    }

    public Optional<Solicitacao> buscarPorId(Long id) {
        return solicitacaoRepository.findById(id);
    }

    public Solicitacao atualizarSolicitacao(Long id, Solicitacao solicitacaoAtualizada) {
        Optional<Solicitacao> solicitacaoExistente = solicitacaoRepository.findById(id);
        
        if (solicitacaoExistente.isPresent()) {
            Solicitacao solicitacao = solicitacaoExistente.get();
            solicitacao.setDescricao(solicitacaoAtualizada.getDescricao());
            solicitacao.setStatus(solicitacaoAtualizada.getStatus());
            solicitacao.setPrioridade(solicitacaoAtualizada.getPrioridade());
            return solicitacaoRepository.save(solicitacao);
        }
        
        return null;
    }

    public void excluirSolicitacao(Long id) {
        solicitacaoRepository.deleteById(id);
    }
}


4.package com.suporte.controller;

import com.suporte.model.Solicitacao;
import com.suporte.service.SolicitacaoService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/solicitacoes")
public class SolicitacaoController {

    @Autowired
    private SolicitacaoService solicitacaoService;

    @PostMapping
    public Solicitacao criarSolicitacao(@RequestBody Solicitacao solicitacao) {
        return solicitacaoService.criarSolicitacao(solicitacao);
    }

    @GetMapping
    public List<Solicitacao> listarSolicitacoes() {
        return solicitacaoService.listarSolicitacoes();
    }

    @GetMapping("/{id}")
    public ResponseEntity<Solicitacao> buscarPorId(@PathVariable Long id) {
        return solicitacaoService.buscarPorId(id)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
    }

    @PutMapping("/{id}")
    public ResponseEntity<Solicitacao> atualizarSolicitacao(
            @PathVariable Long id, 
            @RequestBody Solicitacao solicitacao) {
        Solicitacao solicitacaoAtualizada = solicitacaoService.atualizarSolicitacao(id, solicitacao);
        
        if (solicitacaoAtualizada != null) {
            return ResponseEntity.ok(solicitacaoAtualizada);
        }
        
        return ResponseEntity.notFound().build();
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> excluirSolicitacao(@PathVariable Long id) {
        solicitacaoService.excluirSolicitacao(id);
        return ResponseEntity.noContent().build();
    }
}

XML Básico para rodar o suporte
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.suporte</groupId>
  <artifactId>sistema-suporte-operacional</artifactId>
  <version>1.0.0</version>
  <packaging>jar</packaging>

  <name>sistema-suporte-operacional</name>
  <description>Sistema de Suporte Operacional - API para registro e gerenciamento de incidentes e suporte</description>

  <properties>
    <java.version>11</java.version>
    <spring-boot.version>3.0.0</spring-boot.version>
  </properties>

  <dependencies>
    <!-- Spring Boot Starter Web para APIs RESTful -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- Spring Boot Starter Data JPA para persistência de dados -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

    <!-- Driver do PostgreSQL (ou outro banco de dados que você queira usar) -->
    <dependency>
      <groupId>org.postgresql</groupId>
      <artifactId>postgresql</artifactId>
      <scope>runtime</scope>
    </dependency>

    <!-- Dependência para Testes -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <!-- Plugin do Spring Boot para empacotamento e execução -->
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>


